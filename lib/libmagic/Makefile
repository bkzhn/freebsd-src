# Copyright (c) David E. O'Brien, 2000-2004, 2006, 2009

PACKAGE=lib${LIB}
CONTRDIR=	${SRCTOP}/contrib/file
.PATH: ${CONTRDIR}
.PATH: ${CONTRDIR}/src
.PATH: ${CONTRDIR}/doc

LIB=	magic
SHLIB_MAJOR=	4
.if !make(build-tools)
LIBADD=	z
.endif
MAN=	libmagic.3 magic.5

SRCS=	apprentice.c apptype.c ascmagic.c buffer.c cdf.c cdf_time.c \
	compress.c der.c encoding.c fsmagic.c funcs.c is_json.c \
	is_csv.c \
	is_simh.c \
	is_tar.c magic.c print.c readcdf.c readelf.c seccomp.c softmagic.c
INCS=	magic.h

MAGICPATH?=	/usr/share/misc

CFLAGS+= -DMAGIC='"${MAGICPATH}/magic"' -DHAVE_CONFIG_H
CFLAGS+= -I${.CURDIR} -I${.OBJDIR} -I${CONTRDIR}/src

WARNS?=		3

CLEANFILES+=	magic magic.mgc

FILES=		magic magic.mgc
FILESDIR=	${MAGICPATH}

MAGFILES=	${CONTRDIR}/magic/Header \
		${CONTRDIR}/magic/Localstuff \
		${CONTRDIR}/magic/Magdir/[a-z]*


PACKAGE_VERSION!=	sed -n '/define.*PACKAGE_VERSION/{s,[^0-9.],,gp;q;}' ${.CURDIR}/config.h

libmagic.pc: libmagic.pc.in
	sed -e 's,@prefix@,/usr,g ; \
		s,@exec_prefix@,$${prefix},g ; \
		s,@libdir@,${LIBDIR},g ; \
		s,@sharedlibdir@,${SHLIBDIR},g ; \
		s,@includedir@,${INCLUDEDIR},g ; \
		s,@VERSION@,${PACKAGE_VERSION},g ; \
		s,@LIBS@,,g ;' \
                ${.ALLSRC} > ${.TARGET}

magic: ${MAGFILES}
	cat ${.ALLSRC:O} > ${.TARGET}

magic.mgc: magic
	${BTOOLSPATH:U.}/mkmagic magic

.if ${MACHINE} == "host" || ${MK_DIRDEPS_BUILD} == "no"
CLEANFILES+=	mkmagic
DEPENDOBJS+=	mkmagic
build-tools: mkmagic
# For the non-dirdeps build, HOST_CC is CC by default
mkmagic: apprentice.c cdf_time.c encoding.c funcs.c magic.c print.c ${INCS} ${BUILD_TOOLS_META}
	${HOST_CC:N${CCACHE_BIN}} ${CFLAGS} -DCOMPILE_ONLY ${LDFLAGS} -o ${.TARGET} \
	    ${.ALLSRC:N*.h:O:u} ${LDADD}

.endif
.if ${MK_DIRDEPS_BUILD} == "yes" && ${MACHINE} != "host"
BTOOLSPATH= ${HOST_OBJTOP}/${RELDIR}
.else
magic.mgc: mkmagic
.endif

FILEVER!= awk '$$1 == "\#define" && $$2 == "VERSION" { print $$3; exit }' \
			${.CURDIR}/config.h
CLEANFILES+=	${MAN}
.for mp in ${MAN}
${mp}: ${mp:C/[0-9]/man/}
	sed -e 's/__FSECTION__/5/g' -e 's/__CSECTION__/1/g' \
		-e 's/__VERSION__/${FILEVER}/g' \
		-e 's,__MAGIC__,${MAGICPATH}/magic,g' ${.ALLSRC} > ${.TARGET}
.endfor

CLEANFILES+=	${INCS}
.for inc in ${INCS}
${inc}: ${inc}.in
	sed -e 's,X.YY,${FILEVER:S,",,g:S,.,,g},g' ${.ALLSRC} > ${.TARGET}
.endfor

PCFILES=	libmagic.pc

.include <bsd.lib.mk>
